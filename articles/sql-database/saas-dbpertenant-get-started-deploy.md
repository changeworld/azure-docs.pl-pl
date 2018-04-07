---
title: Samouczek SaaS bazy danych dla dzierżawcy — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Wdrażanie i Eksploruj Wingtip biletów wielodostępnych aplikacji SaaS prezentującą wzorca bazy danych na dzierżawy i innymi wzorami SaaS przy użyciu bazy danych SQL Azure.
keywords: samouczek usługi sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 84e6f2303bcfd6a2c91f29e9e9afdc2b84f990ca
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Wdrażanie i Eksploruj wielodostępnych aplikacji SaaS, który korzysta ze wzorca bazy danych na dzierżawcy z bazy danych SQL

W tym samouczku wdrażanie i Eksploruj aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS (Wingtip). Aplikacja korzysta ze wzorca bazy danych na dzierżawy do przechowywania danych wielu dzierżawców. Aplikacja jest przeznaczona do pokazują funkcje bazy danych SQL Azure, które upraszcza sposób włączania scenariusze SaaS.

Pięć minut po wybraniu **wdrażanie na platformie Azure**, masz wielodostępnych aplikacji SaaS. Aplikacja zawiera bazę danych SQL działa w chmurze. Aplikacja jest wdrażana z trzech dzierżaw próbki, każda z własną bazę danych. Wszystkie bazy danych są wdrażane w puli elastycznej SQL. Aplikacja jest wdrażana na Twojej subskrypcji platformy Azure. Masz pełny dostęp do eksplorowania i pracować z poszczególnych składników aplikacji. C# kodu źródłowego aplikacji i skrypty zarządzania są dostępne w [repozytorium GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip SaaS.
> - Skąd uzyskać źródło aplikacji skryptów kodu i zarządzania.
> - Dotyczące serwerów, pul i baz danych, które tworzą aplikacji.
> - Jak dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Sposób obsługi administracyjnej nowej dzierżawy.
> - Jak monitorować działania dzierżawy w aplikacji.

A [serii samouczków pokrewne](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferuje zapoznać się z różnych wzorców projektowania i zarządzania SaaS. Tworzenie samouczków poza tym początkowe wdrożenie. Użycie samouczków, można zbadać podanych skryptów do implementowania różnych wzorców SaaS w temacie. Skrypty pokazują, jak uprościć tworzenie aplikacji SaaS w funkcji bazy danych SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że jest zainstalowany program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wdrażanie aplikacji SaaS biletów Wingtip

#### <a name="plan-the-names"></a>Planowanie nazwy

W procedurze w tej sekcji musisz podać wartość użytkownika, która jest używana, aby upewnić się, że nazwy zasobów są globalnie unikatowe. Można też podać nazwę grupy zasobów, która zawiera wszystkie zasoby utworzone przez wdrożenie aplikacji. Fikcyjne osoby o nazwie pods Finley zalecamy:

- **Użytkownik**: *af1* składa się z inicjały pods Finley plus cyfrę. W przypadku wdrożenia aplikacji po raz drugi, użyj innej wartości. Przykładem jest af2.
- **Grupa zasobów**: *wingtip-dpt-af1* wskazuje, że jest to aplikacja bazy danych dla dzierżawy. Dołącz af1 nazwy użytkownika służące do skorelowania Nazwa grupy zasobów z nazw zasobów, które zawiera.

Teraz wybierz nazwy, a następnie zapisz je. 

#### <a name="steps"></a>Kroki

1. Aby otworzyć szablon wdrożenia bazy danych dla dzierżawy Wingtip biletów SaaS w portalu Azure, wybierz **wdrażanie na platformie Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Wprowadź wartości w szablonie dla wymaganych parametrów.

    > [!IMPORTANT]
    > Niektóre zapory uwierzytelniania i serwera są celowo niezabezpieczona w celach demonstracyjnych. Firma Microsoft zaleca, aby utworzyć nową grupę zasobów. Nie należy używać istniejącej grupy zasobów, serwerami lub pul. Nie należy używać tej aplikacji, skryptów lub wszystkie zasoby wdrożone w środowisku produkcyjnym. Po zakończeniu pracy z aplikacją w celu zatrzymania rozliczeń powiązane, należy usunąć tę grupę zasobów.

    - **Grupa zasobów**: Wybierz **Utwórz nowy**i Podaj unikatową nazwę wybrano wcześniej dla grupy zasobów. 
    - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej.
    - **Użytkownik**: Użyj wybranymi wartości nazwy użytkownika.

3. Wdrażanie aplikacji.

    a. Zaznacz, aby zaakceptować warunki i postanowienia.

    b. Wybierz **zakupu**.

4. Aby monitorować stan wdrożenia, wybierz **powiadomienia** (ikonę dzwonka z prawej strony pola wyszukiwania). Wdrażanie aplikacji SaaS biletów Wingtip trwa około pięciu minut.

   ![Wdrażanie zakończyło się pomyślnie](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Pobierz i odblokować biletów Wingtip skrypty zarządzania

Podczas wdrażania aplikacji, Pobierz skrypty kodu i zarządzania źródła.

> [!IMPORTANT]
> Zawartość pliku wykonywalnego (skrypty i biblioteki dll) może zostać zablokowany przez system Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębnione. Wykonaj kroki, aby odblokować plik zip, przed wyodrębnić skryptów. Odblokowanie upewnia się, że można uruchamiać skrypty.

1. Przejdź do [repozytorium GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
2. Wybierz **klonowania lub pobierania**.
3. Wybierz **Pobierz ZIP**, a następnie zapisz plik.
4. Kliknij prawym przyciskiem myszy **WingtipTicketsSaaS-DbPerTenant-master.zip** pliku, a następnie wybierz **właściwości**.
5. Na **ogólne** wybierz opcję **Odblokuj** > **Zastosuj**.
6. Wybierz **OK**i Wyodrębnij pliki

Skrypty znajdują się w... \\WingtipTicketsSaaS DbPerTenant wzorca\\modułów uczenia folderu.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizuj plik konfiguracji użytkownika dla tego wdrożenia

Przed uruchomieniem skrypty, zaktualizuj wartości zasobów grup i użytkowników w pliku konfiguracyjnym użytkownika. Ustaw zmienne używane podczas wdrażania.

1. W programie PowerShell ISE Otwórz... \\Modułów szkoleniowych\\**UserConfig.psm1** 
2. Aktualizacja **ResourceGroupName** i **nazwa** z określonymi wartościami dla danego wdrożenia (wierszach 10 i 11 tylko).
3. Zapisz zmiany.

Te wartości są wymienione w niemal każdego skryptu.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Miejsc, które hostują zdarzeń aplikacji, których są wyświetlane. Właściwość typy koncertowe, jazz klubów i sportowych. W biletów Wingtip miejsc jest zarejestrowana jako dzierżaw. Trwa dzierżawcy udostępnia miejsce łatwy sposób listy zdarzeń i sprzedawać bilety klientom. Każda właściwość pobiera spersonalizowane witryny internetowej, aby wyświetlić ich zdarzenia i sprzedawać bilety.

Wewnętrznie w aplikacji, każdy dzierżawca pobiera wdrożonych w puli elastycznej SQL bazy danych SQL.

Centralnego **Centrum zdarzeń** strona zawiera listę łączy do dzierżawców w danym wdrożeniu.

1. Użyj adresu URL, aby otworzyć Centrum zdarzeń w przeglądarce sieci web: http://events.wingtip-dpt.&lt; Użytkownik&gt;. trafficmanager.net. SUBSTITUTE &lt;użytkownika&gt; o wartości użytkownika w danym wdrożeniu.

    ![Centrum zdarzeń](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Wybierz **klub Fabrikam Jazz** w Centrum zdarzeń.

    ![Zdarzenia](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikacja Wingtip używa [ *usługi Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) Aby kontrolować dystrybucję żądań przychodzących. Adres URL strony zdarzenia dostępu do określonych dzierżawcy używany następujący format:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    W poniższej tabeli opisano części poprzedniego formatu.

    | Część adresu URL        | Opis       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Zdarzenia części aplikacji Wingtip.<br /><br /> *-dpt* odróżni *bazy danych dla dzierżawy* implementacji biletów Wingtip z innych implementacji. Przykłady *autonomiczny* aplikacji na dzierżawy (*-sa*) lub *wielodostępnym bazy danych* (*- mt*) implementacji. |
    | .  *&lt;użytkownika&gt;* | *af1* w przykładzie. |
    | .trafficmanager.net/ | Menedżer ruchu bazowy adres URL. |
    | fabrikamjazzclub | Identyfikuje dzierżawy o nazwie klub Jazz firmy Fabrikam. |
    | &nbsp; | &nbsp; |

* Nazwa dzierżawy jest analizowana z adresu URL przez aplikację zdarzeń.
* Nazwa dzierżawy jest używany do utworzenia klucza.
* Klucz jest używany do uzyskać dostęp do katalogu, aby uzyskać lokalizacji dzierżawcy w bazie danych.
    - Katalog jest implementowane za pomocą *zarządzania mapy niezależnego fragmentu*.
* Centrum zdarzeń wykorzystuje rozszerzone metadanych w katalogu do utworzenia adresów URL stron listy z zdarzeń dla każdego dzierżawcy.

W środowisku produkcyjnym zwykle należy utworzyć rekord CNAME DNS, aby [ *punktu firmowej domeny internetowej* ](../traffic-manager/traffic-manager-point-internet-domain.md) nazwa DNS Menedżera ruchu.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona umieśćmy go do pracy.

*LoadGenerator pokaz* obciążeń, do których jest uruchamiana dla wszystkich baz danych dzierżawy uruchamia skrypt programu PowerShell. Rzeczywiste obciążenie wiele aplikacji SaaS jest sporadyczne i nieprzewidywalny. Aby symulować tego rodzaju obciążenia, generator tworzy obciążenia losowego nagłego lub seria działania każdego dzierżawcy. Seria wystąpić losowego odstępach czasu. Trwa wzorzec obciążenia wyłaniać się w ciągu kilku minut. Let generator uruchamiania na co najmniej trzech lub czterech minut przed monitorowania obciążenia.

1. Otwórz w programie PowerShell ISE... \\Modułów uczenia\\narzędzia\\*LoadGenerator.ps1 pokaz* skryptu.
2. Naciśnij klawisz F5, aby uruchomić skrypt i uruchom generator obciążenia. Teraz, należy pozostawić domyślne wartości parametrów.
3. Zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć, jeśli to konieczne.

Uruchamia zadanie w tle dla każdej bazy danych w katalogu skryptu generator obciążenia, a następnie zatrzymuje. Jeśli uruchomisz skryptu generator obciążenia zatrzymuje wszystkie uruchomione przed rozpoczęciem nowych zadań w tle.

#### <a name="monitor-the-background-jobs"></a>Monitorowanie zadań w tle

Jeśli chcesz kontrolować i monitorować zadań w tle, należy użyć następujących poleceń cmdlet:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Wersja demonstracyjna LoadGenerator.ps1 akcje

*Wersja demonstracyjna LoadGenerator.ps1* naśladuje aktywne obciążenia transakcji odbiorcy. W poniższych krokach opisano sekwencję akcji który *LoadGenerator.ps1 pokaz* inicjuje:

1. *Wersja demonstracyjna LoadGenerator.ps1* uruchamia *LoadGenerator.ps1* na pierwszym planie.

    - Oba pliki ps1 są przechowywane w folderach modułów uczenia\\narzędzia\\.

2. *LoadGenerator.ps1* pętlę wszystkie dzierżawy bazy danych w katalogu.

3. *LoadGenerator.ps1* rozpoczyna się w tle zadanie programu PowerShell dla każdej bazy danych dzierżawy:

    - Domyślnie uruchamiane zadania tła na 120 minut.
    - Każde zadanie powoduje, że obciążenie Procesora na podstawie jednego dzierżawcy w bazie danych, wykonując *sp_CpuLoadGenerator*. Intensywność i czas trwania obciążenia może być różna w zależności od `$DemoScenario`. 
    - *sp_CpuLoadGenerator* pętli wokół instrukcję SQL SELECT, która powoduje występowanie wysokie obciążenie procesora CPU. Odstęp czasu między problemów wyboru zależy od wartości parametru, aby utworzyć kontrolowane obciążenie procesora CPU. Poziomów obciążeń i interwały są wybierane do symulowania bardziej realistyczne obciążeń.
    - Plik SQL znajduje się w obszarze *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Jeśli `$OneTime = $false`, generator obciążenia uruchamiania zadań w tle, a następnie kontynuuje działanie. Co 10 sekund monitoruje dla nowi dzierżawcy, które są obsługiwane. Jeśli ustawisz `$OneTime = $true`, LoadGenerator uruchamiania zadań w tle, a następnie zatrzymuje, uruchomiona na pierwszym planie. W tym samouczku, pozostaw `$OneTime = $false`.

  Jeśli chcesz zatrzymać lub uruchomić ponownie generator obciążenia, należy użyć klawiszy Ctrl-C lub zatrzymać operację klawisze Ctrl-Break. 

  Pozostawienie generator obciążenia uruchomiona na pierwszym planie, aby uruchomić inne skrypty programu PowerShell Użyj innego wystąpienia programu PowerShell ISE.

&nbsp;

Przed kontynuowaniem do następnej sekcji, pozostaw generator obciążenia uruchomione w stanie wywoływania zadania.

## <a name="provision-a-new-tenant"></a>Aprowizacja nowej dzierżawy

Początkowe wdrożenie tworzy trzy próbki dzierżaw. Teraz możesz utworzyć innego dzierżawcę, aby zobaczyć wpływ na wdrożonej aplikacji. W aplikacji Wingtip przepływu pracy do obsługi administracyjnej nowych dzierżaw zostało wyjaśnione w dokumencie [samouczek udostępniania i wykaz](saas-dbpertenant-provision-and-catalog.md). Na tym etapie tworzenia nowej dzierżawy, który ma mniej niż minutę.

1. Otwórz nowy PowerShell ISE.
2. Otwórz... \\Learning Modules\Provision i wykaz\\*ProvisionAndCatalog.ps1 pokaz*.
3. Aby uruchomić skrypt, naciśnij klawisz F5. Pozostaw wartości domyślne teraz.

   > [!NOTE]
   > Użyj wielu skryptów Wingtip SaaS *$PSScriptRoot* można przeglądać folderów do wywołania funkcji w innych skryptów. Ta zmienna jest oceniane tylko wtedy, gdy pełna skrypt zostanie wykonany, naciskając klawisz F5. Wyróżnianie i systemem zaznaczenia z F8 może spowodować błędy. Uruchom skrypty, naciśnij klawisz F5.

Nowa baza danych dzierżawy jest:

- Utworzony w puli elastycznej SQL.
- Zainicjowane.
- Zarejestrowane w wykazie.

Po pomyślnym zainicjowaniu obsługi administracyjnej, *zdarzenia* lokacji z nowym dzierżawcą pojawi się w przeglądarce.

![Nowa dzierżawa](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Odśwież Centrum zdarzeń, aby utworzyć nową dzierżawę znajdują się na liście.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Sprawdzanie działania serwerów, pul i baz danych dzierżaw

Teraz, gdy rozpoczęciu uruchamiania obciążenia kolekcję dzierżawców przyjrzymy zasobów, które zostały wdrożone.

1. W [portalu Azure](http://portal.azure.com), przejdź do listy serwerów SQL. Następnie otwórz **katalogu-dpt -&lt;użytkownika&gt;**  serwera.
    - Serwer katalogu zawiera dwie bazy danych, **tenantcatalog** i **basetenantdb** (szablonu bazy danych, która jest kopiowana do utworzenia nowego dzierżawcy).

   ![Bazy danych](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Wróć do listy serwerów SQL.

3. Otwórz **tenants1-dpt -&lt;użytkownika&gt;**  serwera baz danych dzierżawy.

4. Zobacz następujące elementy:

    - Każda baza danych dzierżawy jest **elastycznej standardowe** bazy danych w puli standardowej 50 eDTU.
    - Klon czerwony Racing bazy danych jest bazą danych dzierżawy, aprowizowanej wcześniej.

   ![Serwer z bazami danych](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorowanie puli

Po *LoadGenerator.ps1* działa na kilka minut, za mało danych powinny być dostępne rozpocząć wyszukiwanie informacji niektóre możliwości monitorowania. Te możliwości są wbudowane w pule i baz danych.

Przejdź do serwera **tenants1-dpt -&lt;użytkownika&gt;**i wybierz **Pool1** do wyświetlania wykorzystania zasobów w puli. Na poniższych wykresach generator obciążenia uruchomione na godzinę.

   ![Monitor puli](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Pierwszy wykres z etykietą **wykorzystania zasobów**, użycie jednostek eDTU puli pokazuje.
- Drugi wykres przedstawia użycie eDTU pięć Najaktywniejsze baz danych w puli.

Dwa wykresy pokazują, że pule elastyczne i bazy danych SQL są dobrze nadają się do przewidzenia obciążeń aplikacji SaaS. Wykresy oznaczają, że każdego poszerzająca jak 40 Edtu są cztery bazy danych, które jeszcze wszystkich baz danych wygodnie są obsługiwane przez pulę 50 eDTU. 50 eDTU puli może obsługiwać nawet większych obciążeń. Jeśli bazy danych są udostępniane jako autonomiczny baz danych, każdy z nich musi być S2 (50 DTU) do obsługi seria. Koszt cztery bazy danych S2 autonomiczny jest prawie trzy razy cen puli. W rzeczywistych sytuacji klienci bazy danych SQL Uruchom maksymalnie 500 baz danych w 200 liczbę jednostek eDTU puli. Aby uzyskać więcej informacji, zobacz [samouczek monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby uzyskać więcej informacji, zobacz dodatkowe [samouczki, w aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Aby dowiedzieć się więcej na temat pule elastyczne, zobacz [co to jest puli elastycznej Azure SQL?](sql-database-elastic-pool.md).
- Aby uzyskać informacje dotyczące zadań elastycznych, zobacz [baz danych chmury skalowalnych w poziomie Zarządzaj](sql-database-elastic-jobs-overview.md).
- Aby dowiedzieć się więcej na temat wielodostępnych aplikacji SaaS, zobacz [wzorce dla wielodostępnych aplikacji SaaS projektowe](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip biletów SaaS.
> - Dotyczące serwerów, pul i baz danych, które tworzą aplikacji.
> - Jak dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Sposób obsługi administracyjnej nowych dzierżaw.
> - Jak wyświetlić wykorzystania puli, aby monitorować aktywność dzierżawy.
> - Jak usunąć przykładowych zasobów, aby zatrzymać rozliczeń powiązanego.

Następnie spróbuj [samouczek udostępniania i wykaz](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

