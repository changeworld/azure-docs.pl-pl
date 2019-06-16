---
title: Bazy danych dla dzierżawcy SaaS samouczek — Azure SQL Database | Dokumentacja firmy Microsoft
description: Wdrażanie i eksplorowanie aplikacji wielodostępnej SaaS o nazwie Wingtip Tickets, który demonstruje wzorzec bazy danych dla dzierżawcy i innych wzorców SaaS przy użyciu usługi Azure SQL Database.
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
ms.date: 01/25/2019
ms.openlocfilehash: bbb67845922dd9a3b2a78f76bf25d73bace98a82
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240132"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Wdrażanie i eksplorowanie wielodostępną aplikację SaaS, która używa wzorca bazy danych dla dzierżawcy z usługą SQL Database

W tym samouczku, wdrażanie i eksplorowanie aplikacji bazy danych dla dzierżawcy SaaS o nazwie Wingtip Tickets (o nazwie Wingtip). Aplikacja używa wzorca bazy danych dla dzierżawcy do przechowywania danych wielu dzierżaw. Aplikacja jest przeznaczona do prezentowania funkcji usługi Azure SQL Database, które upraszczają sposób stosowanie scenariuszy SaaS.

W ciągu pięciu minut po wybraniu **Wdróż na platformie Azure**, masz wielodostępną aplikację SaaS. Aplikacja zawiera bazę danych SQL, która działa w chmurze. Aplikacja jest wdrażana z trzema przykładowymi dzierżawami, każdy z własną bazę danych. Wszystkie bazy danych są wdrażane w elastycznej puli SQL. Aplikacja jest wdrażana do subskrypcji platformy Azure. Masz pełny dostęp i pracować z poszczególnych składników aplikacji. C# kod źródłowy aplikacji i skrypty zarządzania są dostępne w [repozytorium GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip SaaS.
> - Gdzie można pobrać źródła aplikacji skryptów kodu i zarządzanie.
> - Dotyczące serwerów, pul i baz danych, które składają się na aplikację.
> - Jak dzierżawcy są mapowane do ich danych za pomocą *katalogu*.
> - Jak wykonać aprowizację nowej dzierżawy.
> - Sposób monitorowania aktywności dzierżawy w aplikacji.

A [seria powiązanych samouczków](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferuje zapoznaj się z różnych wzorców projektowania i zarządzania SaaS. Twórz samouczków po przekroczeniu tego wstępnego wdrożenia. Korzystając z samouczków, można sprawdzić dostarczone skrypty, aby zobaczyć, sposobu implementacji różnych wzorców SaaS. Skrypty pokazują, jak funkcje bazy danych SQL Database upraszczają opracowywanie aplikacji SaaS.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że zainstalowano program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wdrażanie aplikacji SaaS o nazwie Wingtip Tickets

### <a name="plan-the-names"></a>Planowanie nazwy

W krokach w tej sekcji musisz podać wartość użytkownika, który służy do upewnij się, że nazwy zasobów są globalnie unikatowe. Możesz również podać nazwę grupy zasobów, która zawiera wszystkie zasoby utworzone przez wdrożenie aplikacji. Fikcyjne osoby o nazwie Finley pods Sugerujemy:

- **Użytkownik**: *af1* składa się z pods Finley inicjały i cyfry. W przypadku wdrożenia aplikacji po raz drugi użycie innej wartości. Przykładem jest af2.
- **Grupa zasobów**: *wingtip-dpt-af1* wskazuje, że jest to aplikacja bazy danych dla dzierżawcy. Dołącz af1 nazwę użytkownika, aby skorelować nazwę grupy zasobów za pomocą nazw zasobów, które zawiera.

Teraz wybierz nazwy i zanotuj je.

### <a name="steps"></a>Kroki

1. Aby otworzyć szablon wdrożenia bazy danych dla dzierżawcy SaaS o nazwie Wingtip Tickets w witrynie Azure portal, wybierz **Wdróż na platformie Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Wprowadź wartości w szablonie dla wymaganych parametrów.

    > [!IMPORTANT]
    > Niektóre zapory uwierzytelniania i serwera są celowo niezabezpieczone w celach demonstracyjnych. Firma Microsoft zaleca, aby utworzyć nową grupę zasobów. Nie używaj istniejących grup zasobów, serwerów ani pul. Nie należy używać tej aplikacji, skrypty lub wszystkie wdrożone zasoby w środowisku produkcyjnym. Po zakończeniu pracy z aplikacją, aby zatrzymać ich rozliczanie, należy usunąć tę grupę zasobów.

    - **Grupa zasobów**: Wybierz **Utwórz nową**i Podaj unikatową nazwę wybranej wcześniej dla grupy zasobów.
    - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej.
    - **Użytkownik**: Użyj wartości nazwy użytkownika, wybrana wcześniej.

1. Wdrażanie aplikacji.

    a. Zaznacz, aby zaakceptować warunki i postanowienia.

    b. Wybierz pozycję **Kup**.

1. Aby monitorować stan wdrożenia, wybierz pozycję **powiadomienia** (ikona dzwonka z prawej strony pola wyszukiwania). Wdrażanie aplikacji SaaS o nazwie Wingtip Tickets trwa około pięciu minut.

   ![Wdrażanie zakończyło się pomyślnie](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Pobierz i odblokować Wingtip Tickets skrypty zarządzania

Podczas wdrażania aplikacji, Pobierz skrypty źródłowych kodu i zarządzanie.

> [!IMPORTANT]
> Zawartość pliku wykonywalnego (skrypty i biblioteki dll) może być blokowana przez Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębnić. Wykonaj kroki, aby odblokować plik zip, przed wyodrębnić skryptów. Odblokowanie zapewnia, że można uruchamiać skrypty.

1. Przejdź do [repozytorium GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
1. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
1. Wybierz **Pobierz ZIP**, a następnie zapisz plik.
1. Kliknij prawym przyciskiem myszy **WingtipTicketsSaaS-DbPerTenant-master.zip** pliku, a następnie wybierz **właściwości**.
1. Na **ogólne** zaznacz **odblokowanie** > **Zastosuj**.
1. Wybierz **OK**i wyodrębnianie plików

Skrypty znajdują się w... \\WingtipTicketsSaaS DbPerTenant wzorca\\folderze Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Zaktualizuj plik konfiguracji użytkownika, dla tego wdrożenia

Przed uruchomieniem wszystkie skrypty, zaktualizuj wartości zasobów grupy i użytkownika w pliku konfiguracji użytkownika. Ustaw następujące zmienne na wartości, które były używane podczas wdrażania.

1. W środowisku PowerShell ISE Otwórz... \\Learning Modules\\**UserConfig.psm1**
1. Aktualizacja **ResourceGroupName** i **nazwa** z określonymi wartościami dla danego wdrożenia (w wierszach, 10 i 11 tylko).
1. Zapisz zmiany.

Te wartości są wywoływane w niemal każdego skryptu.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikacja prezentuje miejsca, które obsługują zdarzenia. Typów miejsc obejmują koncertowe, kluby jazz i kluby sportowe. W Wingtip Tickets venues są rejestrowane jako dzierżaw. Trwa dzierżawy daje miejsc łatwy sposób imprezy na liście i sprzedawać bilety swoim klientom. Każde miejsce otrzymuje spersonalizowane witryny sieci Web do wyświetlania i sprzedawać bilety.

Wewnętrznie aplikacja, każda dzierżawa pobiera wdrożone w elastycznej puli SQL bazy danych SQL.

Centralna **Centrum zdarzeń** strona zawiera listę linków do dzierżawców w danym wdrożeniu.

1. Użyj adresu URL, aby otworzyć Centrum zdarzeń w przeglądarce sieci web: http://events.wingtip-dpt.&lt; użytkownika&gt;. trafficmanager.net. Zastąp &lt;użytkownika&gt; wartością użytkownika wdrożenia.

    ![Centrum zdarzeń](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Wybierz **Fabrikam Jazz Club** w Centrum zdarzeń.

    ![Events](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikacja Wingtip używa [*usługi Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) do sterowania dystrybucją żądań przychodzących. Adres URL, aby uzyskać dostęp do strony zdarzenia do określonej dzierżawy posługuje się następującym formatem:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    W poniższej tabeli opisano części poprzedniego formatu.

    | Część adresu URL        | Opis       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Zdarzenia części aplikacji Wingtip.<br /><br /> *-dpt* wyróżnia *bazy danych dla dzierżawcy* wdrożenia aplikacji Wingtip Tickets z innych implementacji. Należą do nich *pojedynczego* aplikacji dla dzierżawcy ( *-sa*) lub *wielodostępnej bazie danych* ( *- mt*) implementacji. |
    | .  *&lt;użytkownika&gt;* | *af1* w przykładzie. |
    | .trafficmanager.net/ | Usługi Traffic Manager bazowy adres URL. |
    | fabrikamjazzclub | Identyfikuje dzierżawy o nazwie firmy Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- Nazwa dzierżawy jest analizowana z adresu URL przez aplikację zdarzeń.
- Nazwa dzierżawy jest używany do utworzenia klucza.
- Ten klucz służy dostępu do wykazu uzyskać lokalizację bazy danych dzierżawy.
  - Katalog jest implementowany przy użyciu *procesu zarządzania mapą fragmentów*.
- Centrum zdarzeń używa rozszerzonych metadanych zawartych w katalogu do utworzenia zdarzenia listy adresów URL dla każdego dzierżawcy.

W środowisku produkcyjnym zwykle możesz utworzyć rekordu CNAME DNS, aby [*punktu firmowej domeny internetowej*](../traffic-manager/traffic-manager-point-internet-domain.md) na nazwę DNS usługi Traffic Manager.

> [!NOTE]
> Może nie być od razu widoczne korzystanie z usługi traffic manager jest w tym samouczku. Celem tej serii samouczków jest do prezentowania wzorców, które może obsłużyć skali w środowisku produkcyjnym złożonych. W takim przypadku na przykład, czy masz wiele aplikacji sieci web w różnych miejscach globu, wspólnie z bazami danych i będziesz potrzebować usługi traffic manager kierować między tymi wystąpieniami.
Innego zestawu samouczków, który ilustruje sposób używania usługi traffic manager mimo że są [geoprzywracanie](saas-dbpertenant-dr-geo-restore.md) i [geografickou replikaci](saas-dbpertenant-dr-geo-replication.md) samouczków. W tych samouczkach usługi traffic manager służy do pomocy, aby przełączyć się do odzyskiwania wystąpienie aplikacji SaaS w przypadku regionalnej awarii.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona zbierzmy do pracy.

*LoadGenerator pokaz* skrypt programu PowerShell uruchamia obciążenia, która jest uruchamiana dla wszystkich baz danych dzierżaw. Rzeczywiste obciążenie na wiele aplikacji SaaS jest sporadyczne i nieprzewidywalne. Aby zasymulować ten typ obciążenia, generator tworzy obciążenia przy użyciu losowego wzrostów lub wzrosty aktywności na każdą dzierżawę. Chwilowego występować w odstępach czasu losowego. Trwa kilka minut, zanim wzorca obciążenia wyłaniać się. Pozwolić uruchamiania dla trzech lub czterech minut, zanim monitorowania obciążenia.

1. Otwórz w programie PowerShell ISE... \\Learning Modules\\narzędzia\\*LoadGenerator.ps1 pokaz* skryptu.
2. Naciśnij klawisz F5, aby uruchomić skrypt i Generator obciążenia. Teraz, należy pozostawić domyślne wartości parametrów.
3. Zaloguj się do konta platformy Azure, a następnie wybierz subskrypcję, której chcesz użyć, jeśli to konieczne.

Uruchamia zadanie w tle dla każdej bazy danych w katalogu skryptu generatora obciążenia, a następnie zatrzymuje. Jeśli uruchomisz skrypt generator obciążenia, zatrzymuje wszystkie zadania w tle, które są uruchomione, zanim zacznie nowe.

### <a name="monitor-the-background-jobs"></a>Monitorowanie zadań w tle

Jeśli chcesz kontrolować i monitorować zadania w tle, należy użyć następujących poleceń cmdlet:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Pokaz LoadGenerator.ps1 akcji

*Pokaz LoadGenerator.ps1* naśladuje aktywne obciążenie transakcji odbiorcy. W poniższych krokach opisano sekwencji akcji, która *LoadGenerator.ps1 pokaz* inicjuje:

1. *Pokaz LoadGenerator.ps1* uruchamia *LoadGenerator.ps1* na pierwszym planie.

    - Oba pliki .ps1 są przechowywane w folderach Learning Modules\\narzędzia\\.

2. *LoadGenerator.ps1* pętlę wszystkich baz danych dzierżaw w wykazie.

3. *LoadGenerator.ps1* uruchamia zadania w tle programu PowerShell dla każdej bazy danych dzierżawy:

    - Domyślnie zadania w tle są uruchamiane na 120 minut.
    - Każde zadanie powoduje, że obciążenia procesora CPU w bazie danych jedną dzierżawę, wykonując *sp_CpuLoadGenerator*. Intensywność i czas trwania obciążenia różni się w zależności od `$DemoScenario`.
    - *sp_CpuLoadGenerator* pętli wokół instrukcję SQL SELECT, powodujący wysokie obciążenie procesora CPU. Odstęp czasu między problemów wyboru zależy od wartości parametru, aby utworzyć sterowane obciążenie procesora CPU. Poziomy obciążenia i interwały są wybierane w sposób bardziej realistycznymi obciążeniami.
    - Plik SQL znajduje się w obszarze *WingtipTenantDB\\dbo\\StoredProcedures\\* .

4. Jeśli `$OneTime = $false`, generator obciążenia rozpoczyna się zadania w tle, a następnie kontynuuje działanie. Co 10 sekund monitoruje dla nowych dzierżaw, które są udostępniane. Jeśli ustawisz `$OneTime = $true`, LoadGenerator uruchamia zadania w tle, a następnie zatrzymuje, uruchomiona na pierwszym planie. W tym samouczku pozostaw `$OneTime = $false`.

   Jeśli chcesz zatrzymać lub uruchomić ponownie generator obciążenia, należy użyć klawiszy Ctrl-C lub zatrzymać operację Ctrl-Break.

   Jeśli pozostawisz generator obciążenia uruchomione na pierwszym planie, użyj innego wystąpienia programu PowerShell ISE, aby uruchomić inne skrypty programu PowerShell.

&nbsp;

Przed kontynuowaniem do następnej sekcji pozostawić generator obciążenia, uruchomiony w stanie wywoływanie zadania.

## <a name="provision-a-new-tenant"></a>Aprowizacja nowej dzierżawy

Początkowego wdrożenia są tworzone trzy przykładowe dzierżawy. Teraz możesz utworzyć innej dzierżawy, aby zobaczyć wpływ na wdrożoną aplikację. W aplikacji Wingtip aprowizowanie nowych dzierżaw przepływu pracy zostało wyjaśnione w [samouczek dotyczący aprowizacji i wykazu](saas-dbpertenant-provision-and-catalog.md). Na tym etapie utworzysz nową dzierżawę zajmuje mniej niż minutę.

1. Otwórz nowy program PowerShell ISE.
2. Otwórz... \\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Aby uruchomić skrypt, naciśnij klawisz F5. Teraz, pozostaw wartości domyślne.

   > [!NOTE]
   > Użyj wielu skryptów aplikacji Wingtip SaaS *$PSScriptRoot* można przeglądać folderów mogły wywołać funkcje w innych skryptów. Ta zmienna jest oceniane tylko wtedy, gdy pełna skrypt zostanie wykonany, naciskając klawisz F5. Wyróżnianie i uruchomienie go przy użyciu F8 może spowodować błędy. Aby uruchomić skrypty, naciśnij klawisz F5.

Nowa baza danych dzierżawy jest:

- Utworzone w puli elastycznej SQL.
- Zainicjowane.
- Zarejestrowane w wykazie.

Po pomyślnej aprowizacji *zdarzenia* witryny nowej dzierżawy pojawi się w przeglądarce.

![Nowa dzierżawa](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Odśwież Centrum zdarzeń, aby utworzyć nową dzierżawę, są wyświetlane na liście.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Sprawdzanie działania serwerów, pul i baz danych dzierżaw

Teraz, gdy Zacząłem obciążenia względem kolekcji dzierżaw, Przyjrzyjmy się niektóre zasoby, które zostały wdrożone.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do listy serwerów SQL. Następnie otwórz **catalog-dpt -&lt;użytkownika&gt;**  serwera.
    - Serwer wykazu zawiera dwie bazy danych, **tenantcatalog** i **basetenantdb** (szablonu bazy danych, która jest kopiowana do tworzenia nowych dzierżaw).

   ![Bazy danych](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Wróć do listy serwerów SQL.

3. Otwórz **tenants1-dpt -&lt;użytkownika&gt;**  serwera baz danych dzierżaw.

4. Zobacz następujące elementy:

    - Każda baza danych dzierżawy jest **elastyczna — standardowa** bazy danych w puli standardowej 50 eDTU.
    - Red Maple Racing bazy danych jest baza danych dzierżawy, aprowizowanej wcześniej.

   ![Serwer z bazami danych](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorowanie puli

Po *LoadGenerator.ps1* działa przez kilka minut, wystarczającej ilości danych powinny być dostępne rozpocząć przyglądanie się niektóre możliwości monitorowania. Te możliwości są wbudowane w pul i baz danych.

Przejdź do serwera **tenants1-dpt -&lt;użytkownika&gt;** i wybierz **Pool1** do wyświetlenia wykorzystania zasobów puli. W następujące wykresy generator obciążenia uruchomionych za jedną godzinę.

   ![Monitorowanie puli](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Pierwszy wykres, etykietą **wykorzystanie zasobów**, pokazuje użycie jednostek eDTU w puli.
- Drugi wykres przedstawia użycie jednostek eDTU pięciu najbardziej aktywnych baz danych w puli.

Dwa wykresy pokazują, że elastyczne pule i bazy danych SQL są dobrze nadaje się do nieprzewidywalnych obciążeń aplikacji SaaS. Wykresy pokazują, że każdy przenoszenie obsługi dużego ruchu do możliwie jak 40 jednostek Edtu są cztery bazy danych, a jeszcze wszystkich baz danych wygodnie są obsługiwane przez pulę 50 eDTU. 50 eDTU puli mogą obsługiwać nawet większych obciążeń. Jeśli bazy danych są aprowizowane jako pojedynczych baz danych, każdy z nich musi być S2 (50 jednostek DTU) do obsługi chwilowego. Koszt cztery pojedynczych baz danych S2 jest prawie trzykrotnie przekraczałby koszt puli. W rzeczywistych warunkach klienci bazy danych SQL, uruchamiać do 500 baz danych w pulach 200 jednostek eDTU. Aby uzyskać więcej informacji, zobacz [samouczek monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby uzyskać więcej informacji, zobacz dodatkowe [samouczków, które są kompilowane w aplikacji SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Aby dowiedzieć się więcej o elastycznych pulach, zobacz [co to jest pula elastyczna Azure SQL?](sql-database-elastic-pool.md).
- Aby dowiedzieć się więcej o zadaniach elastycznych, zobacz [Zarządzanie bazami danych w chmurze skalowanych w poziomie](elastic-jobs-overview.md).
- Aby dowiedzieć się więcej o wielodostępnych aplikacji SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację SaaS o nazwie Wingtip Tickets.
> - Dotyczące serwerów, pul i baz danych, które składają się na aplikację.
> - Jak dzierżawcy są mapowane do ich danych za pomocą *katalogu*.
> - Jak wykonać aprowizację nowych dzierżaw.
> - Jak wyświetlić wykorzystania puli w celu monitorowania aktywności dzierżawy.
> - Jak usunąć przykładowych zasobów w celu zakończenia ich rozliczania.

Następnie spróbuj [samouczek dotyczący aprowizacji i wykazu](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
