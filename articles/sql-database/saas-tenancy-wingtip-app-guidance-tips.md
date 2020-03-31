---
title: Przykład aplikacji dla wielu dzierżawców — Wingtip SaaS
description: Zawiera kroki i wskazówki dotyczące instalowania i uruchamiania przykładowej aplikacji z wieloma dzierżawami, która używa usługi Azure SQL Database, przykład SaaS Wingtip Tickets.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132295"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Ogólne wskazówki dotyczące pracy z przykładowymi aplikacjami SaaS Wingtip Tickets

Ten artykuł zawiera ogólne wskazówki dotyczące uruchamiania przykładowych aplikacji SaaS korzystających z usługi Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Pobieranie i odblokowywanie skryptów SaaS Wingtip Tickets

Zawartość wykonywalna (skrypty, biblioteki DLL) może być blokowana przez system Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębniane. Podczas wyodrębniania skryptów z pliku zip **wykonaj poniższe czynności, aby odblokować plik .zip przed wyodrębnieniem**. Dzięki temu skrypty mogą być uruchamiane.

1. Przejdź do repozytorium Wingtip Tickets SaaS GitHub dla wzorca dzierżawy bazy danych, który chcesz zbadać:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kliknij **pozycję Klonuj lub pobierz**.
3. Kliknij **pozycję Pobierz kod pocztowy** i zapisz plik.
4. Kliknij prawym przyciskiem myszy plik zip, a następnie wybierz polecenie **Właściwości**. Nazwa pliku zip będzie odpowiadać nazwie repozytorium. (np. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na karcie **Ogólne** wybierz pozycję **Odblokuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty znajdują się w *.. \\Moduły szkoleniowe.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Praca ze skryptami programu PowerShell Wingtip Tickets

Aby w pełni wykorzystać wyeksliku próbki, należy zagłębić się w dostarczone skrypty. Użyj punktów przerwania i krok po kroku skryptów, jak one wykonać i sprawdzić, jak różne wzorce SaaS są implementowane. Aby łatwo przejść przez dostarczone skrypty i moduły w celu uzyskania najlepszego zrozumienia, zalecamy użycie [programu PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizowanie pliku konfiguracyjnego dla wdrożenia

Edytuj plik **UserConfig.psm1** z grupą zasobów i wartością użytkownika ustawioną podczas wdrażania:

1. Otwórz *powershell ISE* i załaduj ... \\Moduły\\szkoleniowe*UserConfig.psm1*
2. Zaktualizuj nazwę i *nazwę* *resourcegroup* z określonymi wartościami dla wdrożenia (tylko w wierszach 10 i 11).
3. Zapisz zmiany!

Ustawienie tych wartości w tym miejscu po prostu chroni przed koniecznością aktualizacji tych wartości specyficznych dla wdrożenia w każdym skrypcie.

### <a name="execute-the-scripts-by-pressing-f5"></a>Wykonywanie skryptów przez naciśnięcie klawisza F5

Kilka skryptów używa *$PSScriptRoot* do nawigacji po folderach, a *$PSScriptRoot* jest oceniana tylko wtedy, gdy skrypty są wykonywane przez naciśnięcie **klawisza F5**.Wyróżnianie i uruchamianie zaznaczenia (**F8**) może spowodować błędy, więc naciśnij **klawisz F5** podczas uruchamiania skryptów.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Przechodzenie przez skrypty w celu zapoznania się z implementacją

Najlepszym sposobem, aby zrozumieć skrypty jest przez przechodzenie przez nich, aby zobaczyć, co robią. Zapoznaj się z dołączonymi skryptami **demonstracyjnymi,** które stanowią łatwy do naśladowania przepływ pracy wysokiego poziomu. Skrypty **Demo-pokazują** kroki wymagane do wykonania każdego zadania, więc ustawić punkty przerwania i drążyć głębiej w poszczególnych wywołań, aby wyświetlić szczegóły implementacji dla różnych wzorców SaaS.

Porady dotyczące eksplorowania i przechodzenia przez skrypty programu PowerShell:

- Otwórz skrypty **demo** w programie PowerShell ISE.
- Wykonaj lub kontynuuj z **F5** (przy użyciu **F8** nie jest *zalecane,* ponieważ $PSScriptRoot nie jest oceniana podczas uruchamiania wyborów skryptu).
- Umieść punkty przerwania, klikając lub zaznaczając wiersz i naciskając klawisz **F9**.
- Pomiń wywołanie funkcji lub skryptu, używając klawisza **F10**.
- Wejdź do wywoływanej funkcji lub skryptu, używając klawisza **F11**.
- Wyjdź z bieżącego wywołania funkcji lub skryptu, używając kombinacji klawiszy **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Badanie schematu bazy danych i wykonywanie zapytań SQL za pomocą aplikacji SSMS

Program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) umożliwia łączenie serwerów aplikacji i baz danych oraz przeglądanie ich.

Wdrożenie początkowo ma dzierżawców i katalog serwerów bazy danych SQL do nawiązania połączenia. Nazewnictwo serwerów zależy od wzorca dzierżawy bazy danych (szczegóły znajdują się poniżej).

   - **Autonomiczna aplikacja:** serwery dla każdej dzierżawy (np. *contosoconcerthall-&lt;&gt; User* Server) i *catalog-sa-&lt;User&gt; *
   - **Baza danych na dzierżawcę:** *&lt;tenants1-dpt- Użytkownik&gt; * i *katalog-dpt-&lt;Serwery użytkowników&gt; *
   - **Wielodostępna baza danych:** *tenants1-mt-&lt;User&gt; * and *catalog-mt-&lt;Serwery użytkowników&gt; *

Aby zapewnić pomyślne połączenie demonstracyjne, wszystkie serwery mają [regułę zapory](sql-database-firewall-configure.md) umożliwiającą przejście wszystkich serwerów IP.


1. Otwórz *SSMS* i połącz się z dzierżawcami. Nazwa serwera zależy od wybranego wzorca dzierżawy bazy danych (szczegółowe informacje znajdują się poniżej):
    - **Samodzielna aplikacja:** serwery poszczególnych dzierżawców (np. *contosoconcerthall-&lt;&gt;Użytkownik .database.windows.net*)
    - **Baza danych na dzierżawcę:** *&lt;tenants1-dpt- User&gt;.database.windows.net*
    - **Baza danych z wieloma dzierżawami:** *tenants1-mt-&lt;User&gt;.database.windows.net*
2. Kliknij **pozycję Podłącz** > **aparat bazy danych...**:

   ![serwer wykazu](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Poświadczenia demo to: Login = *developer*, Hasło = *P\@ssword1*

    Poniższy obraz ekspozycyjnie dla bazy danych na wzorzec *dzierżawy.*
    ![Połączenia](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Powtórz kroki 2-3 i połącz się z serwerem wykazu (poniżej znajdują się konkretne nazwy serwerów na podstawie wybranego wzorca dzierżawy bazy danych)
    - **Samodzielna aplikacja:** *catalog-sa-&lt;User&gt;.database.windows.net*
    - **Baza danych na dzierżawcę:** *catalog-dpt-&lt;User&gt;.database.windows.net*
    - **Baza danych z wieloma dzierżawami:** *catalog-mt-&lt;User&gt;.database.windows.net*


Po pomyślnym podłączeniu powinny być widoczne wszystkie serwery. Lista baz danych może być inna, w zależności od dzierżawy zostały zainicjowane.

Obraz poniżej przedstawia logowanie dla bazy danych na wzorzec *dzierżawy.*

![eksplorator obiektów](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Następne kroki
- [Wdrażanie aplikacji autonomicznej SaaS Wingtip Tickets](saas-standaloneapp-get-started-deploy.md)
- [Wdrażanie bazy danych SaaS biletów Wingtip na aplikację dzierżawcy](saas-dbpertenant-get-started-deploy.md)
- [Wdrażanie aplikacji Bazy danych wielodostępnych Wingtip Tickets SaaS](saas-multitenantdb-get-started-deploy.md)

